# C接口文档

- 索引

    - [简介](#简介)：简介

    - [APIs](#APIs)：C API 接口定义

    - [附录](#附录)：四类算法接口定义

---

## 简介

本文档提供了ViMo C SDK所有接口的描述、方法定义及参数说明等内容。

如果您在环境配置上有困难，请参考 **requirements.md** 配置文档。如果您在调用过程中遇到了问题，请参考 **FAQ.md** 常见问题文档。

如果您想了解关于 ViMo SDK 的更多详细使用方法（如初始化、运行等）以及对应场景的调用方法，请参考 **documentation.md** 调用文档。

---

## APIs

该SDK为C++编写的动态链接库，并根据C++动态库封装了面向过程的C接口，根据C接口封装了C#接口。

- C++ 接口请参考 **API.md**
- C# 接口请参考 **CShaep_API.md**

C SDK提供3个主要接口：

- SDK实例创建接口

  | 分类   | 参数        | 定义             | 数据类型       |
  | ------ | ----------- | ---------------- | -------------- |
  | 参数   | sdk         | SDK指针          | series_sdk_t * |
  | 参数   | config_path | 解决方案文件路径 | const char *   |
  | 返回值 | ResultCode  | 状态码           | int            |

- SDK解决方案初始化接口

  | 分类   | 参数          | 定义                    | 数据类型       |
  | ------ | ------------- | ----------------------- | -------------- |
  | 参数   | sdk           | SDK指针                 | series_sdk_t * |
  | 参数   | typeName      | 图像类别名称            | const char *   |
  | 参数   | solutionId    | 解决方案ID              | const int      |
  | 参数   | useGpu        | 是否启用NVIDIA GPU推理  | const bool     |
  | 参数   | deviceId      | 设备（NVIDIA GPU）ID    | const int      |
  | 参数   | enablePrinter | 是否启用SDK结果日志输出 | const bool     |
  | 返回值 | ResultCode    | 状态码                  | const int      |

- SDK推理接口

  | 分类   | 参数       | 定义       | 数据类型            |
  | ------ | ---------- | ---------- | ------------------- |
  | 参数   | sdk        | SDK指针    | series_sdk_t *      |
  | 参数   | req        | 请求体指针 | series_request_t *  |
  | 参数   | rsp        | 响应体指针 | series_response_t * |
  | 返回值 | ResultCode | 状态码     | int                 |

---

### 公共接口

公共接口为`c_vimo_series_sdk.h`中的公共成员。其目的是为其他接口的参数或函数返回类型。

- series_request_t

    - 功能说明：

        C接口的SDK请求结构体，对应C++中的`smartmore::series::SeriesRequest`，`c_sdk_run()`函数的入参。

    - 类型定义：

        ```c
        typedef struct
        {
            void *req;
        } series_request_t;
        ```

    - 成员描述：

        | 成员 | 说明 |
        | :- | :- |
        | req | SDK请求体指针，指向C++动态库中对应的请求体，使用`c_req_and_rsp_init()`初始化|

- series_response_t

    - 功能说明：

        C接口的SDK响应结构体，对应C++中的`smartmore::series::SeriesResponse`，`c_sdk_run()`函数的出参。

    - 类型定义：

        ```c
        typedef struct
        {
            void *req;
        } series_response_t;
        ```

    - 成员描述：

        | 成员 | 说明 |
        | :- | :- |
        | rsp | SDK请求体指针，指向C++动态库中对应的响应体，使用`c_req_and_rsp_init()`初始化，`c_sdk_run()`后更新|

---

### SDK实例创建接口

SDK实例创建接口为`c_vimo_series_sdk.h`中`c_sdk_create()`函数。

- 功能说明：

    串联SDK实例创建接口。通过读取传入ViMo平台后导出的`config.json`配置文件，创建SDK实例，返回`int`状态码。

- 类型定义：

    ```c
    int c_sdk_create(series_sdk_t *sdk,
                     const char *config_path);
    ```

- 参数描述：

    | 参数名 | 类型 | 说明 |
    | :- | :- | :- |
    | sdk | series_sdk_t * | SDK实例指针 |
    | config_path | const char * | ViMo平台导出的`config.json`配置文件路径（需要保证ViMo平台导出的`models`文件夹与`config.json`文件处于同一级文件目录，以确保能够正常读取模型文件） |

- 返回描述：

    返回`int`类型状态码，对应C++中`ResultCode`状态码，即`0`为正常，`1`为失败。

---

### SDK解决方案初始化接口

SDK解决方案初始化接口为`c_vimo_series_sdk.h`中的`c_sdk_init()`函数和`c_req_and_rsp_init()`函数。分别用作SDK实例初始化接口、SDK请求体与响应体初始化接口。

- c_sdk_init

    - 功能说明：

        串联SDK解决方案初始化接口。通过传入`config.json`配置文件中的图像类别、解决方案ID等，初始化选定的串联解决方案，返回`int`状态码。

    - 类型定义：

        ```c
        int c_sdk_init(series_sdk_t *sdk,
                       const char *type_name,
                       const int solution_id,
                       const bool use_gpu,
                       const int device_id,
                       const bool enable_printer);
        ```

    - 参数描述：

        | 参数名 | 类型 | 说明 |
        | :- | :- | :- |
        | sdk | series_sdk_t * | SDK实例指针 |
        | type_name | const char * | 图像类别名称 |
        | solution_id | const int | 解决方案ID |
        | use_gpu | const bool | 是否启用NVIDIA GPU推理（若为`true`则推理会在GPU上运行，否则会在CPU上运行） |
        | device_id | const int | 设备（NVIDIA GPU）ID（若有n个GPU则取值范围在$[0, n-1]$，作用是选择GPU设备进行推理，若超出取值范围（即选择的设备不存在）会导致程序崩溃） |
        | enable_printer | const bool | 是否启用SDK结果日志输出 |

    - 返回描述：

        返回`int`类型状态码，对应C++中`ResultCode`状态码，即`0`为正常，`1`为失败。

- c_req_and_rsp_init

    - 功能说明：

        串联SDK请求体与响应体初始化接口。通过传入SDK指针、请求体指针、响应体指针、图片路径，初始化请求体与响应体。

    - 类型定义：

        ```c
        void c_req_and_rsp_init(series_sdk_t *sdk,
                                series_request_t *req,
                                series_response_t *rsp,
                                const char *input_path);
        ```

    - 参数描述：

        | 参数名 | 类型 | 说明 |
        | :- | :- | :- |
        | sdk | series_sdk_t * | SDK实例指针 |
        | req | series_request_t * | 请求体指针 |
        | rsp | series_response_t * | 响应体指针 |
        | input_path | const char * | 输入的图片路径，用于初始化请求体，由调用者输入<br />图片可以是三通道彩色(CV\_8UC3)的图像或者单通道(CV\_8UC1)的灰度图 |

---

### SDK推理接口

SDK推理接口为`c_vimo_series_sdk.h`中的`c_sdk_run()`函数。

- 功能说明：

    串联SDK解决方案推理接口。根据通过传入请求体与响应体进行推理，将最终算法推理结果写入响应体中，返回`int`状态码。

    需要注意的是同一个实例中，第一次推理需要初始化CUDA，故花费时间较长。

- 类型定义：

    ```c
    int c_sdk_run(series_sdk_t *sdk,
                  const series_request_t *req,
                  series_response_t *rsp);
    ```

- 参数描述：

    | 参数名 | 类型 | 说明 |
    | :- | :- | :- |
    | sdk | series_sdk_t * | SDK实例指针 |
    | req | series_request_t * | 请求体指针，是算法推理的输入 |
    | rsp | series_response_t * | 响应体指针，是算法推理的输出 |

---

### SDK释放接口

SDK释放接口为`c_vimo_series_sdk.h`中的`c_sdk_delete()`函数。

- 功能说明：

    释放内部申请的内存。

- 类型定义：

    ```c
    void c_sdk_delete(series_sdk_t *sdk,
                      series_request_t *req,
                      series_response_t *rsp);
    ```

- 参数描述：

    | 参数名 | 类型 | 说明 |
    | :- | :- | :- |
    | sdk | series_sdk_t * | SDK实例指针 |
    | req | series_request_t * | 请求体指针 |
    | rsp | series_response_t * | 响应体指针 |

---

## 附录

### Classification

本节的接口为`cls_common/Common.h`中的公共类。为`Any`类型四类算法中的分类算法类型的请求体与响应体。

| 接口                   | 定义                               |
| ---------------------- | ---------------------------------- |
| ClassLabel             | 分类类别结构体，包含类别信息的标签 |
| ClassificationRequest  | 分类推理的请求体                   |
| ClassificationResponse | 分类推理的响应体                   |

- ClassLabel

  - 功能说明：

    分类类别结构体，包含类别信息的标签。

  - 类型定义：

    ```c++
    struct ClassLabel
    {
        int id;
        std::string name;
        float score;
    };
    ```

  - 成员描述：

    | 成员  | 类型        | 说明                                        |
    | :---- | :---------- | :------------------------------------------ |
    | id    | int         | 类别的id                                    |
    | name  | std::string | 类别的名称                                  |
    | score | float       | 推理得分，即该类别的置信度，取值范围$[0,1]$ |

- ClassificationRequest

  - 功能说明：

    分类推理的请求体。

  - 类型定义：

    ```c++
    struct ClassificationRequest
    {
        cv::Mat image;
        std::vector<float> threshold;
    };
    ```

  - 成员描述：

    | 成员      | 类型                 | 说明                                                         |
    | :-------- | :------------------- | :----------------------------------------------------------- |
    | image     | cv::Mat              | 输入的图像，可以是三通道彩色(`CV\_8UC3`)的图像或者单通道(`CV\_8UC1`)的灰度图 |
    | threshold | std::vector\<float\> | 各个分类置信度的阈值数组，大小与类别数量相同，顺序与ViMo平台前端显示相同，取值范围$[0,1]$。阈值过低可能会导致信噪比低，而阈值过高则可能会过滤掉正确的结果。选择合适的阈值以获得更好的结果 |

- ClassificationResponse

  - 功能说明：

    分类推理的响应体。

  - 类型定义：

    ```c++
    struct ClassificationResponse
    {
        std::vector<ClassLabel> labels;
        QCCode code = QCCode::NotAvailable;
    };
    ```

  - 成员描述：

    | 成员   | 类型                      | 说明                                                       |
    | :----- | :------------------------ | :--------------------------------------------------------- |
    | labels | std::vector\<ClassLabel\> | 类别标签的链表，表示算法识别出所有可能分类                 |
    | code   | QCCode                    | 质量检测结果（已弃用，推理给出的结果只会是`NotAvailable`） |

---

### Detection

本节的接口为`det_common/Common.h`中的公共类。为`Any`类型四类算法中的检测算法类型的请求体与响应体。

| 接口              | 定义                         |
| ----------------- | ---------------------------- |
| BoxInfo           | 检测推理检测到的物体的包围盒 |
| DetectionRequest  | 检测推理的请求体             |
| DetectionResponse | 检测推理的响应体             |

- BoxInfo

  - 功能说明：

    检测推理检测到的物体的包围盒，这里的坐标系与OpenCV相同：以图像的左上角为原点，x轴水平向右，y轴垂直向下。

  - 类型定义：

    ```c++
    struct BoxInfo
    {
        int32_t label_id;       
        std::string label_name; 
    
        float score; 
        float xmin;  
        float xmax;  
        float ymin;  
        float ymax;  
    };
    ```

  - 成员描述：

    | 成员       | 类型        | 说明                                    |
    | :--------- | :---------- | :-------------------------------------- |
    | label_id   | int32_t     | 当前物体的标签id                        |
    | label_name | std::string | 当前物体的标签名                        |
    | score      | float       | 当前物体检测的置信度，取值范围$[0,1]$   |
    | xmin       | float       | 包围盒中x坐标的最小值，单位为像素的个数 |
    | xmax       | float       | 包围盒中x坐标的最大值，单位为像素的个数 |
    | ymin       | float       | 包围盒中y坐标的最小值，单位为像素的个数 |
    | ymax       | float       | 包围盒中y坐标的最大值，单位为像素的个数 |

    - 备注：
      - `xmin`和`ymin`组成包围盒的左上角
      - `xmax`和`ymax`组成包围盒的右下角

- DetectionRequest

  - 功能说明：

    检测推理的请求体。

  - 类型定义：

    ```c++
    struct DetectionRequest
    {
        cv::Mat image;
        float threshold; 
    };
    ```

  - 成员描述：

    | 成员      | 类型    | 说明                                                         |
    | :-------- | :------ | :----------------------------------------------------------- |
    | image     | cv::Mat | 输入的图像，可以是三通道彩色(`CV\_8UC3`)的图像或者单通道(`CV\_8UC1`)的灰度图 |
    | threshold | float   | 检测置信度的阈值，阈值过低可能会导致信噪比低，而阈值过高则可能会过滤掉正确的结果。选择合适的阈值以获得更好的结果 |

- DetectionResponse

  - 功能说明：

    检测推理的响应体。

  - 类型定义：

    ```c++
    struct DetectionResponse
    {
        std::vector<BoxInfo> box_list; 
        QCCode code;
    };
    ```

  - 成员描述：

    | 成员     | 类型                   | 说明                                                      |
    | :------- | :--------------------- | :-------------------------------------------------------- |
    | box_list | std::vector\<BoxInfo\> | 一个包围盒的数组，表示检测到物体                          |
    | code     | QCCode                 | 已弃用，质量检测结果，推理给出的结果只会是`kNotAvailable` |

---

### Segmentation

本节的接口为`seg_common/Common.h`中的公共类。为`Any`类型四类算法中的分割算法类型的请求体与响应体。

| 接口                 | 定义             |
| -------------------- | ---------------- |
| SegmentationRequest  | 分割推理的请求体 |
| SegmentationResponse | 分割推理的响应体 |

- SegmentationRequest

  - 功能说明：

    分割推理的请求体。

  - 类型定义：

    ```c++
    struct SegmentationRequest
    {
        cv::Mat image;
        std::vector<int> thresholds;
    };
    ```

  - 成员描述：

    | 成员       | 类型               | 说明                                                         |
    | :--------- | :----------------- | :----------------------------------------------------------- |
    | image      | cv::Mat            | 输入的图像，可以是三通道彩色(`CV\_8UC3`)的图像或者单通道(`CV\_8UC1`)的灰度图 |
    | thresholds | std::vector\<int\> | 阈值的数组，大小与类型数目相同，顺序与ViMo平台前端显示相同。阈值的大小代表的是像素的个数，会过滤掉每个分类中面积小于阈值的结果。阈值过低可能会导致信噪比低，而阈值过高则可能会过滤掉正确的结果。选择合适的阈值以获得更好的结果 |

- SegmentationResponse

  - 功能说明：

    分割推理的响应体。

  - 类型定义：

    ```c++
    struct SegmentationResponse
    {
        cv::Mat mask;
        std::map<int, std::string> names;
        QCCode code;
    };
    ```

  - 成员描述：

    | 成员  | 类型                         | 说明                                                         |
    | :---- | :--------------------------- | :----------------------------------------------------------- |
    | mask  | cv::Mat                      | 图像分割过后的掩码图像，这些掩码构成的连通域就是分割的结果，连通域中的值即是其分类id |
    | names | std::map\<int, std::string\> | 包含每个分类的id和其对应的名称的一个表，可以查询`mask`中不同区域的分类名称 |
    | code  | QCCode                       | 已弃用，质量检测结果，推理给出的结果只会是`NotAvailable`     |

---

### OCR

本节的接口为`ocr_common/Common.h`中的公共类。为`Any`类型四类算法中的OCR算法类型的请求体与响应体。

| 接口        | 定义             |
| ----------- | ---------------- |
| Coordinate  | 坐标结构体       |
| TextBlock   | 字符包围盒结构体 |
| OCRResponse | OCR推理的响应体  |
| OCRRequest  | OCR推理的请求体  |

- Coordinate

  - 功能说明：

    坐标结构体，表示二维平面上的一个点（这里的坐标系与OpenCV相同：以图像的左上角为原点，x轴水平向右，y轴垂直向下）。

  - 类型定义：

    ```c++
    struct Coordinate
    {
        float x, y;
    };
    ```

  - 成员描述：

    | 成员 | 类型  | 说明                    |
    | :--- | :---- | :---------------------- |
    | x    | float | x坐标，以像素个数为单位 |
    | y    | float | y坐标，以像素个数为单位 |

- TextBlock

  - 功能说明：

    字符包围盒结构体。

  - 类型定义：

    ```c++
    struct TextBlock
    {
        std::vector<Coordinate> polygon;
        std::string text;
        float confidence;
    };
    ```

  - 成员描述：

    | 成员       | 类型                      | 说明                                                         |
    | :--------- | :------------------------ | :----------------------------------------------------------- |
    | polygon    | std::vector\<Coordinate\> | 包含字符包围盒的顶点坐标的链表，按顺时针顺序排列，第一个顶点是包围盒的左上角 |
    | text       | std::string               | 该包围盒中的字符，编码为`utf-8`                              |
    | confidence | float                     | 该字符识别的置信度，表示算法对这个结果的正确性的判断         |

- OCRRequest

  - 功能说明：

    OCR推理的请求体。

  - 类型定义：

    ```c++
    struct OCRRequest
    {
        cv::Mat image;
        float threshold;
    };
    ```

  - 成员描述：

    | 成员      | 类型    | 说明                                                         |
    | :-------- | :------ | :----------------------------------------------------------- |
    | image     | cv::Mat | 输入的图像，可以是三通道彩色(`CV\_8UC3`)的图像或者单通道(`CV\_8UC1`)的灰度图 |
    | threshold | float   | 模型推理分数的阈值，合法的取值范围是$[0,1]$。阈值会过滤掉置信率低的结果，阈值过低可能会导致信噪比低，而阈值过高则可能会过滤掉正确的结果。选择合适的阈值以获得更好的结果 |

- OCRResponse

  - 功能说明：

    OCR推理的响应体。

  - 类型定义：

    ```c++
    struct OCRResponse
    {
        std::vector<TextBlock> blocks;
        QCCode code;
    };
    ```

  - 成员描述：

    | 成员   | 类型                     | 说明                                                       |
    | :----- | :----------------------- | :--------------------------------------------------------- |
    | blocks | std::vector\<TextBlock\> | 算法识别到的所有字符包围盒数组                             |
    | code   | QCCode                   | 已弃用，质量检查的结果，推理给出的结果只会是`NotAvailable` |

---