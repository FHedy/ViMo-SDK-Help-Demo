# 配置文档

本文档为ViMo串联SDK配置文档，内容包括ViMo SDK环境依赖配置方法。

- 如果您在调用过程中遇到了问题，请参考`FAQ.md`常见问题文档。
- 如需了解详细的调用过程，请参考 `documentation.md`调用文档。
- 如果您想了解更多ViMo SDK相关接口的描述、方法定义及参数说明等内容，请参考：
  - C++ 接口文档：`API.md`
  - C 接口文档：`C_API.md`
  - C# 接口文档：`CSharp_API.md`

---

## 环境依赖条件

- OpenCV 4.2.0

- ONNXRuntime 1.7.0

- CUDA 11.0（GPU版）

- cuDNN 8.0 for CUDA 11.0（GPU版）

---

## 配置依赖

### OpenCV

OpenCV4已打包在sdk中，Linux版本在lib目录下，Win版本在bin目录下

- Win10
  - 方法一：将sdk的bin目录下的opencv_world420.dll与opencv_world420.lib放到构建好的dll或可执行文件目录下
  - 方法二：把opencv的dll所在的路径加入windows环境变量中

- Linux
  - 将sdk的opencv目录下的lib添加到LD_LIBRARY_PATH中

---

### ONNXRuntime

ONNXRuntime已打包在sdk中，Linux版本在lib目录下，Win版本在bin目录下

- Win10
  - 方法一：将sdk的bin目录底下的onnxruntime.dll与onnxruntime.lib放到构建好的dll或可执行文件目录下
  - 方法二：把opencv的dll所在的路径加入windows环境变量中
- Linux
  - 方法一：将sdk的lib目录下的libonnxruntime.so与libonnxruntime.so.1.7.0放到构建好的dll或可执行文件目录下
  - 方法二：将相关动态库的路径加入LD_LIBRARY_PATH中

---

### CUDA

详细可参考https://developer.nvidia.com/cuda-11.0-download-archive

- Win10 - 手动安装

  - 下载[CUDA Toolkit 11.0](https://pan.baidu.com/s/164L6yOpOd-X3q0lHVmfilw)（提取码：knx0），可以离线安装[local]，也可在线下载安装[network]
  - 运行安装程序，根据屏幕提示进行安装
  - 添加系统环境变量

  ```
  CUDA_PATH = C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v11.0
  CUDA_PATH_V11_0 = C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v11.0
  CUDA_SDK_PATH = C:\ProgramData\NVIDIA Corporation\CUDA Samples\v11.0 
  CUDA_LIB_PATH = %CUDA_PATH%\lib\x64 
  CUDA_BIN_PATH = %CUDA_PATH%\bin 
  CUDA_SDK_BIN_PATH = %CUDA_SDK_PATH%\bin\win64 
  CUDA_SDK_LIB_PATH = %CUDA_SDK_PATH%\common\lib\x64
  ```

- Ubuntu - 手动安装

  - 下载[CUDA Toolkit 11.0](https://pan.baidu.com/s/164L6yOpOd-X3q0lHVmfilw)（提取码：knx0），可以离线安装[local]，也可在线下载安装[network]
  - 运行安装程序，根据屏幕提示进行安装
  - 添加系统路径

  ```bash
  export CUDA_HOME=/usr/local/cuda 
  export PATH=$PATH:$CUDA_HOME/bin 
  export LD_LIBRARY_PATH=/usr/local/cuda-11.0/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
  ```

  - 前面的 `export` 命令只会使命令对运行它的终端会话可用。

    你可以编辑 shell 配置文件，永久地添加这些命令。 Linux 提供了许多不同的 shell，每个都有不同的配置文件。 例如：

    - **Bash Shell**：~/.bash_profile 或 ~/.bashrc
    - **Korn Shell**：~/.kshrc 或 .profile
    - **Z Shell**：~/.zshrc 或 .zprofile

---

### cuDNN

详细可参考https://docs.nvidia.com/deeplearning/cudnn/install-guide/index.html

- Win10 - 手动安装
  - 下载[CUDA 11.0 对应版本的cuDNN](https://developer.nvidia.com/compute/machine-learning/cudnn/secure/8.0.2.39/11.0_20200724/cudnn-11.0-linux-x64-v8.0.2.39.tgz)
  - 解压后替换CUDA安装目录下的相应文件
- Ubuntu - 手动安装
  - 下载[CUDA 11.0 对应版本的cuDNN](https://developer.nvidia.com/compute/machine-learning/cudnn/secure/8.0.4/11.0_20200923/Ubuntu18_04-x64/libcudnn8_8.0.4.30-1+cuda11.0_amd64.deb)
  - 解压后替换CUDA安装目录下的相应文件

---

## 验证依赖

### Nidia驱动版本查看方法

- Win10/Linux通用方法：

  - 打开终端/CMD/Powershell
  - 输入nvidia-smi

  - “Drive Version”字段即为Nvidia驱动版本

- Win10：

  - 打开“NVIDIA 控制信息面板”
  - 在程序菜单栏中选择“帮助” --> “系统信息”
  - 点击界面中“显示”选项卡 -->  “图形卡信息”子类别 --> “细节” --> “驱动程序版本”，即为Nvidia驱动版本

---

### CUDA版本查看方法

- Win10/Linux通用方法： 

  - 打开终端/CMD/Powershell
  - 输入nvcc --version
  - “Cuda compilation tools”字段即为CUDA版本

---

### cuDNN版本查看方法

- Win10/Linux通用方法：

  - 查看CUDA安装路径中include/cudnn_version.h，如下示例中，cuDNN版本为8.0.5

    ```c++
    #define CUDNN_MAJOR 8
    #define CUDNN_MINOR 0
    #define CUDNN_PATCHLEVEL 5
    ```
  
- Linux:

  - 打开终端
  - 输入`cat $(whereis cudnn_version.h | awk '{print $2}') | grep CUDNN_MAJOR -A 2`即可获取版本信息

---