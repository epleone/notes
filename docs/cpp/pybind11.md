# pybind11 


## preface

[简介 - pybind11中文文档](https://charlottelive.github.io/pybind11-Chinese-docs/00.%E4%BB%8B%E7%BB%8D.html)

[191123 使用 Pybind11 和 OpenCV 创建 Python 库 - 知乎](https://zhuanlan.zhihu.com/p/93299698)

[keras-unet-deploy/cpp/libunet at master · ausk/keras-unet-deploy · GitHub](https://github.com/ausk/keras-unet-deploy/tree/master/cpp/libunet)

[GitHub - tdegeus/pybind11\_examples: Examples for the usage of "pybind11"](https://github.com/tdegeus/pybind11_examples/tree/master)


[windows下使用pybind11生成python调用c++模块（超详细一看就会）\_pybind11 windows-CSDN博客](https://blog.csdn.net/m0_59156726/article/details/128786995)

[聊聊Python ctypes 模块 - 知乎](https://zhuanlan.zhihu.com/p/20152309)


[微信公众平台](https://mp.weixin.qq.com/s/lcd_a9ZvUaw4zUVkti57pw)


pybind11 需要c++17的依赖

```cmake

# 后缀名改成pyd能够被python引用
set_target_properties(libname PROPERTIES SUFFIX ".pyd")

```



## 配置

网上下载 pybind zip  [GitHub - pybind/pybind11: Seamless operability between C++11 and Python](https://github.com/pybind/pybind11)


```cmake
# 要么提供 "Findpybind11.cmake" 
# 要么手动设置 `pybind11_DIR` 的路径
set(pybind11_DIR "${CMAKE_SOURCE_DIR}/pybind11-2.11.1")


pybind11_add_module(${LIBNAME} ${SRC_FILES} ${HEADER_FILES})

```


[c++ - Unknown CMake command "pybind11\_add\_module" - Stack Overflow](https://stackoverflow.com/questions/74689827/unknown-cmake-command-pybind11-add-module)


## numpy


### numpy  cv::Mat 相互转换

``` c++
#include <opencv2/core/core.hpp>
#include <opencv2/highgui/highgui.hpp>
#include <opencv2/imgproc/imgproc.hpp>
#include <pybind11/pybind11.h>
#include <pybind11/stl.h>
#include <pybind11/numpy.h>

namespace py = pybind11;


namespace pybind11 {
    namespace detail {

        // cv::Mat 与 numpy.ndarray 之间转换
        // Python 支持一种通用的插件间数据交换缓冲区协议(buffer protocol)。
        // 让类型暴露一个缓冲区视图(buffer view), 这样可以对原始内部数据进行直接访问，常用于矩阵类型。
        //
        //Pybind11 提供了 py::buffer_info 类型，来映射 Python 缓冲区协议(buffer protocol)。
        //
        //struct buffer_info {
        //    void* ptr;                      /* Pointer to buffer */
        //    ssize_t itemsize;               /* Size of one scalar */
        //    std::string format;             /* Python struct-style format descriptor */
        //    ssize_t ndim;                   /* Number of dimensions */
        //    std::vector<ssize_t> shape;     /* Buffer dimensions */
        //    std::vector<ssize_t> strides;    /* Strides (in bytes) for each index */
        //};

        template<>
        struct type_caster<cv::Mat> {
        public:

            PYBIND11_TYPE_CASTER(cv::Mat, _("numpy.ndarray"));

            //! 1. cast numpy.ndarray to cv::Mat
            bool load(handle obj, bool) {
                array b = reinterpret_borrow<array>(obj);
                buffer_info info = b.request();

                int nh = 1;
                int nw = 1;
                int nc = 1;
                int ndims = info.ndim;
                if (ndims == 2) {
                    nh = info.shape[0];
                    nw = info.shape[1];
                }
                else if (ndims == 3) {
                    nh = info.shape[0];
                    nw = info.shape[1];
                    nc = info.shape[2];
                }
                else {
                    throw std::logic_error("Only support 2d, 2d matrix");
                    return false;
                }

                int dtype;
                if (info.format == format_descriptor<unsigned char>::format()) {
                    dtype = CV_8UC(nc);
                }
                else if (info.format == format_descriptor<int>::format()) {
                    dtype = CV_32SC(nc);
                }
                else if (info.format == format_descriptor<float>::format()) {
                    dtype = CV_32FC(nc);
                }
                else {
                    throw std::logic_error("Unsupported type, only support uchar, int32, float");
                    return false;
                }
                value = cv::Mat(nh, nw, dtype, info.ptr);
                return true;
            }

            //! 2. cast cv::Mat to numpy.ndarray
            static handle cast(const cv::Mat& mat, return_value_policy, handle defval) {
                std::string format = format_descriptor<unsigned char>::format();
                size_t elemsize = sizeof(unsigned char);
                int nw = mat.cols;
                int nh = mat.rows;
                int nc = mat.channels();
                int depth = mat.depth();
                int type = mat.type();
                int dim = (depth == type) ? 2 : 3;
                if (depth == CV_8U) {
                    format = format_descriptor<unsigned char>::format();
                    elemsize = sizeof(unsigned char);
                }
                else if (depth == CV_32S) {
                    format = format_descriptor<int>::format();
                    elemsize = sizeof(int);
                }
                else if (depth == CV_32F) {
                    format = format_descriptor<float>::format();
                    elemsize = sizeof(float);
                }
                else {
                    throw std::logic_error("Unsupport type, only support uchar, int32, float");
                }

                std::vector<size_t> bufferdim;
                std::vector<size_t> strides;
                if (dim == 2) {
                    bufferdim = { (size_t)nh, (size_t)nw };
                    strides = { elemsize * (size_t)nw, elemsize };
                }
                else if (dim == 3) {
                    bufferdim = { (size_t)nh, (size_t)nw, (size_t)nc };
                    strides = { (size_t)elemsize * nw * nc, (size_t)elemsize * nc, (size_t)elemsize };
                }
                return array(buffer_info(mat.data, elemsize, format, dim, bufferdim, strides)).release();
            }
        };
    }
}

```



### 直接处理numpy 数组


``` c++
py::array_t<float> run(py::array_t<unsigned char>& a, py::array_t<unsigned char>& b)
	{
		py::buffer_info a_info = a.request();
		py::buffer_info b_info = b.request();
	
		if (a_info.shape != b_info.shape)
		{
			throw std::logic_error("Error: The input array shapes are not equal, check plz..");
		}

		int ndims = a_info.ndim;

		if (2 == ndims || 3 == ndims) 
		{
			// support (h, w, 1)
			if (3 == ndims  && 1 != a_info.shape[2])
			{
				throw std::logic_error("Error: The input array shapes not (h, w) or (h, w, 1), check plz..");
			}

			if (a_info.shape[0] != im_h || a_info.shape[1] != im_w)
			{
				throw std::logic_error("Error: The input array (height, width) not (" + std::to_string(im_h) + "," + std::to_string(im_w) + "), check plz..");
			}

		}
		else 
		{
			throw std::logic_error("Error: Only support 2d matrix");
		}


		unsigned char* I0_data = (unsigned char*)a_info.ptr;    // unsigned char
		unsigned char* I1_data = (unsigned char*)b_info.ptr;    // unsigned char

		py::array_t<float> flow_({ max_h, max_w, 2 });
		py::buffer_info flow_info = flow_.request();
		float* flow_data = (float*)flow_info.ptr;				// float

	}

```


``` c++
// 函数接受一个NumPy数组作为参数，并返回数组中所有元素的总和
double sum_of_array(py::array_t<double> array) {
    // 获取NumPy数组的指针
    auto ptr = static_cast<double *>(array.request().ptr);

    // 获取数组的形状信息
    auto shape = array.shape();
    size_t size = shape[0] * shape[1]; // 计算数组中元素的总数

    // 计算数组中所有元素的总和
    double sum = 0.0;
    for (size_t i = 0; i < size; i++) {
        sum += ptr[i];
    }

    return sum;
}

```

## list


python的list已经和c++的vector绑定。


比如将一个一维列表转成二维列表。


c++的实现：

``` c++
std::vector<std::vector<unsigned char>> run(const std::vector<unsigned char>& input_list) {
		
		std::vector<std::vector<unsigned char>> output_list;
		for (size_t i = 0; i < input_list.size(); i += 2) {
			output_list.push_back({ input_list[i], input_list[i + 1] });
		}

		return output_list;
	}

```


python的调用：

``` python

y = run([1, 2, 3, 4, 5, 6])
print(y)

# output
[[1, 2], [3, 4], [5, 6]] 

```