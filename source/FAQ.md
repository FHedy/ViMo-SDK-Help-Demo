# 常见问题文档

本文介绍ViMo SDK调用过程中的常见问题及其解决方法。

- 如果在环境配置上有困难，请参考 `requirements.md` 配置文档。
- 如需了解详细的调用过程，请参考 `documentation.md`调用文档。
- 如果您想了解更多ViMo SDK相关接口的描述、方法定义及参数说明等内容，请参考：
  - C++ 接口文档：`/doc/API.md`
  - C 接口文档：`/doc/C_API.md`
  - C# 接口文档：`/doc/CSharp_API.md`

---

## 问题一 如何处理OpenCV报错？

### 问题描述

- 出现报错信息（OpenCV报错信息）：

    ```bash
    OpenCV: terminate handler is called! The last OpenCV error is:
    OpenCV(4.2.0) Error: Assertion failed (!ssize.empty()) in cv::resize, file C:\build\master_winpack-build-win64-vc15\opencv\modules\imgproc\src\resize.cpp, line 4045
    ```

### 解决方法

- 原因：

    - 图片路径错误导致图片读取错误。

    - 图片无读取权限导致图片读取错误。

- 解决方法：

    - 检查图片路径是否正确、是否含有非法字符。

    - 检查图片权限是否可读。

---

## 问题二 如何处理ONNXRuntime报错？

### 问题描述

- 出现报错信息（ONNXRuntime报错信息）：

    ```bash
    [E:onnxruntime:, sequential_executor.cc:339 onnxruntime::SequentialExecutor::Execute] Non-zero status code returned while running Relu node. Name:'Relu_2' Status Message: CUDA error cudaErrorNoKernelImageForDevice:no kernel image is available for execution on the device
    ```

### 解决方法

- 原因：

    不支持当前版本CUDA/CuDNN或当前显卡。

- 解决方法：

    升级至requirements.md推荐CUDA/CuDNN版本，或升级显卡。

---

## 问题三 如何处理推理日志？

### 问题描述

- 推理后输出日志：

    ```bash
    [DET] pre-process time: 0.008s
    [DET] inference time: 0.053s
    [DET] post-process time: 0.001s
    ```

### 解决方法

- 原因：

    正常推理日志，`pre-process time`表示前处理时间，`inference time`表示前处理时间，`post-process time`表示后处理时间。

---

## 问题四 为何要确保`config.json`与`models`路径同级？

### 问题描述

- 为什么需要保证`Create()`函数传入的`config.json`与平台导出的`models`文件夹路径处于同级目录？

### 解决方法

- 原因：

    SDK需要通过`config.json`读取同级目录下`models`文件夹中的模型文件。

---

## 问题五 是否支持多图推理？

### 问题描述

- SDK是否支持多图推理？

### 解决方法

- 解决方法：

    如果需要多线程推理，请创建多个模组并初始化相应解决方案。需要注意的是，初始化等操作非线程安全，不能在多个线程中同时初始化同一个解决方案。

---

## 问题六 如何计算分割推理结果的缺陷面积？

### 问题描述

- 如何计算分割推理结果的缺陷面积？

### 解决方法

- 解决方法一：

    C++接口可以自行转化分割推理结果，使用OpenCV提供的方法进行计算。可参考解决方案二中的示例。

- 解决方法二：

    所有接口均可以通过SDK的结果输出类进行计算面积，在SDK提供的示例中：

    ```c++
    case smartmore::series::SDKType::kSegmentation:
    {
        if (rsp.any_rsp.empty())
        {
            std::cerr << "no seg result!\n";
        }
    
        for (const auto &r : rsp.any_rsp)
        {
            for (int i = 1; i < 11; i++)
            {
                auto tmp = r.AnyCast<smartmore::segmentation::SegmentationResponse>();
                cv::Mat label_mask = (tmp.mask == static_cast<uint8_t>(i));
                cv::Mat cc_out;
                int cc_num = cv::connectedComponents(label_mask, cc_out, 8);
    
                for (size_t j = 1; j < cc_num; j++)
                {
                    cv::Mat cc_tmp = (cc_out == static_cast<uint8_t>(j));
    
                    std::vector<std::vector<cv::Point>> contours;
                    std::vector<cv::Vec4i> hierarcy;
                    cv::findContours(cc_tmp, contours, hierarcy, cv::RETR_CCOMP, cv::CHAIN_APPROX_SIMPLE);
                    std::cout << "size of contours: " << contours.size() << std::endl;
                    for (int k = 0; k < contours.size(); k++)
                    {
                        if (contours[k].size() > 7)
                        {
                            for (const auto &contour : contours[k])
                            {
                                std::cout << "(" << contour.x << " " << contour.y << ")";
                            }
                            std::cout << std::endl;
                        }
                    }
                }
            }
        }
    
        break;
    }
    ```

    其中，`contours.size()`为缺陷轮廓面积。

---