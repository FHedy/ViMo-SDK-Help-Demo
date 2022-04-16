# ViMo SDK Help
## 简介

串联 SDK 串联推理分类、检测、分割以及 OCR 四类算法，保留一个统一的入口接口，通过选择解决方案进行串联推理。

## SDK文件目录

```bash
.
├── bin # Linux static libraries or Windows libraries
├── cmake # cmake files
├── doc # documentation
├── include # header file
├── lib # Linux shared libraries or Windows pyd file
├── opencv # C++ opencv libraries
└── tutorial # tutorial in C++
```

- `bin`: 打包好的Linux静态库/Windows动态库，包括OpenCV、ONNXRuntime等
- `cmake`：SDK cmake文件
- `doc`：SDK 相关使用文档
  - API接口文档：
    - C++ 接口文档：`C++ API.md`
    - C 接口文档：`C_API.md`
    - C# 接口文档：`CSharp_API.md`
  - SDK 调用方法：`documentation.md`
  - 依赖要求及安装方法：`requirements.md`
  - FAQ文档：`FAQ.md`
- `include`：头文件
- `lib`：Linux 动态库/Windows Python 接口 PYD 文件
- `opencv`：打包好的 OpenCV
- `tutorial`：SDK C++ 实例

## 软件/硬件配置

1. SDK支持的平台及开发环境

| Items    | Specifications                   |
| -------- | -------------------------------- |
| CPU要求  | i7，i9                           |
| 显卡要求 | Nvidia 显卡20 系列，至少 8G 显存  |
| 操作系统 | Windows 10，Linux               |

2. 软件环境依赖要求
   - OpenCV 4.2.0
   - ONNXRuntime 1.7.0
   - CUDA 11.0（GPU 版）
   - cuDNN 8.0 for CUDA 11.0（GPU 版）

3. 配置依赖

   基于依赖要求准备软件环境时，请参考[依赖要求及安装方法](./requirements.md)中的**配置依赖**。

4. 验证环境准备是否正确

   配置依赖后需验证环境准备是否正确，请参考[依赖要求及安装方法](./requirements.md)中的**验证依赖**。

## API接口说明

关于 ViMo SDK 相关接口的描述、方法定义及参数说明等内容，请参考：

- [C++ 接口文档](./APIs/C++ API.md)
- [C 接口文档](./APIs/C_API.md)
- [C# 接口文档](./APIs/CSharp_API.md)

## SDK调用说明

关于 ViMo SDK 的详细调用流程及调用方法示例，请参考[SDK 调用方法](./documentation.md)。

## FAQ

关于 ViMo SDK 的常见问题及解答，请参考[FAQ文档](./FAQ.md)。

