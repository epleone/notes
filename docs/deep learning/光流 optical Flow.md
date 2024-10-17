# 光流 optical Flow

## 原理

[FlowNet和FlowNet2.0相关论文阅读笔记 - 知乎](https://zhuanlan.zhihu.com/p/685238417)

## 传统方法

[光流算法：从传统算法到深度学习](https://zhuanlan.zhihu.com/p/660866515)

## 深度学习方法

FlowNet2

We use RAFT to extract optical flow in our approach.

Raft: Recurrent all-pairs field transforms for optical flow.

## 数据

目前模型评估普遍利用的是KITTI2012, KITTI2015, Sintel Clean, Sintel Final 4个数据集。

在光流计算性能指标上，5个最重要的指标分别是端点误差(End-Point Error, EPE)、平均端点误差(Average End-Point Error, AEPE)、每秒帧速率(Frame Per Second,FPS)、角度误差(Angular Error,AE)和平均角度误差(Average Angular Error,AAE)。其中EPE为估计光流和真实光流之间的欧氏距离，用来衡量光流估计的准确程度，AE常用于评估角度误差，二者是互补的，AE对小幅度运动误差敏感，EPE对大幅度运动误差敏感。其定义分别为

![图片](flow_estimation.png)

## 可视化

[GitHub - tomrunia/OpticalFlow\_Visualization: Python optical flow visualization following Baker et al. (ICCV 2007) as used by the MPI-Sintel challenge](https://github.com/tomrunia/OpticalFlow_Visualization)

[python - Visualize Optical Flow with color model - Stack Overflow](https://stackoverflow.com/questions/28898346/visualize-optical-flow-with-color-model)

``` c++

// 使用opencv实现python可视化代码
// https://github.com/tomrunia/OpticalFlow_Visualization/blob/master/flow_vis/flow_vis.py

static uchar colorWheelArray[] = { 255,   0,   0, 255,  17,   0, 255,  34,   0, 255,  51,   0, 255,  68,   0, 255,  85,   0, 255, 102,   0, 255, 119,   0, 255, 136,   0, 255, 153,   0, 255, 170,   0,
                                   255, 187,   0, 255, 204,   0, 255, 221,   0, 255, 238,   0, 255, 255,   0, 213, 255,   0, 170, 255,   0, 128, 255,   0, 85, 255,   0, 43, 255,   0, 0, 255,   0,
                                   0, 255,  63, 0, 255, 127, 0, 255, 191, 0, 255, 255, 0, 232, 255, 0, 209, 255, 0, 186, 255, 0, 163, 255, 0, 140, 255, 0, 116, 255, 0,  93, 255,
                                   0,  70, 255, 0,  47, 255, 0,  24, 255, 0,   0, 255, 19,   0, 255, 39,   0, 255, 58,   0, 255, 78,   0, 255, 98,   0, 255, 117,   0, 255,
                                   137,   0, 255, 156,   0, 255, 176,   0, 255, 196,   0, 255, 215,   0, 255, 235,   0, 255, 255,   0, 255, 255,   0, 213, 255,   0, 170, 255,   0, 128, 255,   0,  85, 255,   0,  43, };

static Mat makecolorWheel()
{
    // 初始化一个55x3的数组
    cv::Mat colorWheel = cv::Mat(55, 3, CV_8U, colorWheelArray);

    return colorWheel;
}

static Mat flowuv2Colors(const cv::Mat& flowU, const cv::Mat& flowV)
{
    cv::Mat colorWheel = makecolorWheel();

    int ncols = colorWheel.rows;

    cv::Mat rad;
    cv::sqrt(flowU.mul(flowU) + flowV.mul(flowV), rad);

    cv::Mat a(flowV.size(), CV_32F);

    for (int i = 0; i < flowV.rows; ++i) {
        for (int j = 0; j < flowV.cols; ++j) {
            float vy = flowV.at<float>(i, j);
            float ux = flowU.at<float>(i, j);

            float angle = std::atan2(-vy, -ux);

            while (angle < -CV_PI) angle += 2 * CV_PI;
            while (angle > CV_PI) angle -= 2 * CV_PI;

            a.at<float>(i, j) = angle / CV_PI;
        }
    }

    cv::Mat fk = ((a + 1.0) / 2.0) * (ncols - 1);

    cv::Mat k0;
    fk.convertTo(k0, CV_32S);
    cv::Mat k1 = k0 + 1;
    k1.setTo(0, k1 >= ncols); 

    cv::Mat k0_f;
    k0.convertTo(k0_f, CV_32F);
    cv::Mat f = fk - k0_f;

    std::vector<cv::Mat> channels;

    for (int i = 0; i < colorWheel.cols; i++) {
        cv::Mat tmp = colorWheel.col(i);
        cv::Mat col0(flowU.size(), CV_32F);
        cv::Mat col1(flowU.size(), CV_32F);

        for (int row = 0; row < flowU.rows; ++row) {
            for (int col = 0; col < flowU.cols; ++col) {
                col0.at<float>(row, col) = colorWheel.at<uchar>(k0.at<int>(row, col), i) / 255.0;
                col1.at<float>(row, col) = colorWheel.at<uchar>(k1.at<int>(row, col), i) / 255.0;
            }
        }

        cv::Mat col = (1.0 - f).mul(col0) + f.mul(col1);

        cv::Mat idx = rad <= 1;
        cv::Mat rslt1 = 1 - rad.mul(1 - col);
        rslt1.copyTo(col, idx);

        cv::Mat rslt2 = col * 0.75;
        rslt2.copyTo(col, ~idx);

        cv::Mat img;
        cv::convertScaleAbs(col, img, 255);
        channels.push_back(img);

    }

    cv::Mat flowColor;
    cv::merge(channels, flowColor);
    return flowColor;
}

static Mat flow2Color(const cv::Mat& flow)
{
    assert(flow.channels() == 2);

    std::vector<cv::Mat> vecFlow;
    cv::split(flow, vecFlow);

    cv::Mat& flowU = vecFlow[0];
    cv::Mat& flowV = vecFlow[1];

    cv::Mat flowRad;
    cv::sqrt(flowU.mul(flowU) + flowV.mul(flowV), flowRad);

    double minRad, maxRad = 0.0;
    double eps = 1e-5;
    cv::minMaxLoc(flowRad, &minRad, &maxRad);

    flowU = flowU / (maxRad + eps);
    flowV = flowV / (maxRad + eps);

    return flowuv2Colors(flowU, flowV);
}
```


> [!quote]
> 1. [视觉光流计算技术及其应用-腾讯云开发者社区-腾讯云](https://cloud.tencent.cn/developer/article/2321685)
>

